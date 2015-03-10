# Image_SO2
Create an image of the So2 map

%program to plot the SO2 data using the red/blue color map, scaling the
%concentrations for the figure alpha.
%V0 CLB 1/16/15
close all
clear all
cfiles = dir('*_V7_Conc.mat');
load('ROI');

%for debugging
%for ifile = 1:1
for ifile = 1:size(cfiles,1)
    fname = cfiles(ifile).name;
    m = matfile([fname], 'Writable', true);
    
    Nj = m.Nj;
    Nk = m.Nk;
    NFrames = m.NFrames;
    NWavelength = m.NWavelength;
    
    %%
    [mapOut, indexOut] = map_SO2(m.I);
    %%
    
    scaled_C = m.Conc/(max(m.Conc)-min(m.Conc));
    reshaped_C = reshape(scaled_C,Nj,Nk,NFrames);
    SO2_plot = reshape(indexOut,Nj,Nk,NFrames);
    frame_sel = Region(ifile).Frame;
    m.SO2_avg = zeros(size(frame_sel,2),1);
    
    
    for iframe = 1:size(frame_sel,2)
        %calculate %SO2 in ROI
        ROI_SO2(:,:,iframe) = squeeze(SO2_plot(:,:,frame_sel(iframe))).*Region(ifile).ROI(:,:,iframe);
        ROI_C(:,:,iframe) = reshaped_C(:,:,frame_sel(iframe)).*Region(ifile).ROI(:,:,iframe);
        scaledROIC(:,:,iframe) = ROI_C(:,:,iframe)./(max(max((ROI_C(:,:,iframe))))-min(min(ROI_C(:,:,iframe))));
        m.SO2_avg(iframe,1) = sum(nonzeros((ROI_SO2(:,:,iframe)/255)))/size(nonzeros(ROI_SO2(:,:,iframe)),1);
        %     ROI_SO2 = squeeze(SO2_plot(:,:)).*Region(ifile).ROI(:,:);
        %     ROI_C = reshaped_C.*Region(ifile).ROI(:,:);
        
        %     scaledROIC = ROI_C./(max(max((ROI_C)))-min(min(ROI_C)));
        
        %m.SO2_avg = sum(nonzeros((ROI_SO2.*scaledROIC)/255))/size(nonzeros(ROI_SO2),1)/100;
        
        %     m.SO2_avg = sum(nonzeros((ROI_SO2/255)))/size(nonzeros(ROI_SO2),1);
        
        % figure;image(SO2_plot(:,:)); colormap(mapOut);
        figure;image(SO2_plot(:,:,frame_sel(iframe))); colormap(mapOut);
        alpha(10*reshaped_C(:,:));
        m = matfile([fname], 'Writable', false);
    end
end
%MIP_SO2 = mean(SO2_plot,3);
